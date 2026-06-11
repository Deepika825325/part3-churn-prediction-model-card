# Model Card: D2C Customer Churn Prediction Model

**Version:** 1.0
**Last Updated:** June 2026
**Model Owner:** Data Science Team
**Status:** Production-Ready (Validation Complete)

---

## 1. Model Overview

This model predicts the probability that a customer of a Direct-to-Consumer (D2C) personal care brand will churn within the next 60 days.

**Core purpose:** Enable the retention team to identify at-risk customers proactively and trigger targeted, personalised interventions — rather than applying blanket retention campaigns to all customers or reacting only after churn has already occurred.

**What this model is not:** A guarantee that a customer will churn. It is a decision-support tool that surfaces risk signals. Every prediction should be reviewed in business context before action is taken.

---

## 2. Model Details

| Attribute | Value |
|---|---|
| Model Type | Logistic Regression |
| Problem Type | Binary Classification |
| Target Variable | `churn_next_60d` (1 = churned, 0 = retained) |
| Prediction Horizon | 60 days from snapshot date |
| Decision Threshold | 0.45 |
| Training Framework | scikit-learn 1.9+ |
| Preprocessing Pipeline | ColumnTransformer (StandardScaler + OneHotEncoder + SimpleImputer) |
| Input Feature Count | 25 raw features → 43 encoded features |
| Training Set Size | 1,728 customers |
| Validation Set Size | 336 customers |
| Test Set Size | 336 customers |
| Best CV Score (F1) | 0.7783 (5-fold) |
| Best Hyperparameter | C = 1 (regularisation strength) |

### Why Logistic Regression Was Selected

Four models were evaluated through full hyperparameter tuning before the final selection:

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC | PR-AUC |
|---|---|---|---|---|---|---|
| **Logistic Regression** | **0.8155** | **0.8058** | **0.7619** | **0.7832** | **0.8827** | **0.8676** |
| Random Forest | 0.8036 | 0.8000 | 0.7347 | 0.7660 | 0.8794 | 0.8585 |
| Gradient Boosting | 0.7946 | 0.7671 | 0.7619 | 0.7645 | 0.8776 | 0.8637 |
| XGBoost | 0.7917 | 0.7550 | 0.7755 | 0.7651 | 0.8799 | 0.8644 |

Logistic Regression achieved the highest Accuracy, F1, ROC-AUC, and PR-AUC. It also offers interpretable coefficients — a practical advantage when explaining model behaviour to marketing, CRM, and product stakeholders. XGBoost achieved marginally higher Recall (0.776 vs 0.762), but this advantage was outweighed by inferior performance on all other metrics.

---

## 3. Intended Use

### Primary Use Cases

- **Retention campaign targeting:** Score all active customers weekly and flag those above the 0.45 threshold for outreach by the CRM or retention team.
- **CRM risk scoring:** Embed churn probability as a customer attribute in the CRM dashboard to inform customer success conversations.
- **Campaign prioritisation:** Rank at-risk customers by predicted probability to allocate limited retention budget to highest-risk customers first.
- **Customer health monitoring:** Track customers whose scores cross key thresholds over time to detect deteriorating engagement patterns before they reach critical levels.

### Who Should Use This

- Retention marketing teams running outreach campaigns
- CRM and customer success teams prioritising outbound contact
- Product teams monitoring cohort health
- Analytics teams tracking churn trends over time

### Intended Users Are Expected To

- Review model predictions before acting on them, especially for high-value customers
- Apply business judgement alongside model output (e.g., if a Gold-tier customer is flagged, verify the signal before sending a discount)
- Report anomalous predictions to the modelling team for investigation

---

## 4. Data Used

The model was trained on a customer-level snapshot containing behavioural data observed **on or before the snapshot date**. No forward-looking information was used.

### Dataset Summary

| Source | Description | Size |
|---|---|---|
| Customer profiles | Demographics, signup date, loyalty tier | 2,400 customers |
| Order history | Purchases, amounts, returns, ratings | 10,009 orders |
| Support tickets | Issue types, sentiment, resolution times | 1,921 tickets |
| Web/app events | Sessions, views, cart/wishlist activity | Snapshot table |
| Campaign engagement | Email opens, campaign clicks | Snapshot table |
| Churn labels | Ground truth: churned within 60 days | 2,400 records |

**Overall churn rate in dataset:** 46.96% (1,127 churned / 1,273 retained out of 2,400 customers)

**Class distribution across splits:**

| Split | Non-Churn | Churn |
|---|---|---|
| Train (n=1,728) | 53.01% | 46.99% |
| Validation (n=336) | 56.25% | 43.75% |
| Test (n=336) | 50.00% | 50.00% |

### Feature Inventory

**Customer Profile (6 features)**

| Feature | Type | Description |
|---|---|---|
| `city_tier` | Categorical | Tier 1 / Tier 2 / Tier 3 city classification |
| `age_group` | Categorical | 18–24, 25–34, 35–44, 45+ |
| `acquisition_channel` | Categorical | Instagram, Google Search, Marketplace, Referral, Organic, Influencer |
| `loyalty_tier` | Categorical | Bronze, Silver, Gold, Platinum (NaN if unassigned) |
| `preferred_category` | Categorical | Skin Care, Hair Care, Makeup, Fragrance, Baby Care, Wellness |
| `marketing_consent` | Categorical | Yes / No |

**Purchase Behaviour (7 features, 180-day window)**

| Feature | Type | Description |
|---|---|---|
| `recency_days` | Numeric | Days since most recent purchase |
| `frequency_180d` | Numeric | Number of purchases in last 180 days |
| `monetary_180d` | Numeric | Total spend (₹) in last 180 days |
| `return_rate_180d` | Numeric | Proportion of orders returned |
| `avg_discount_pct_180d` | Numeric | Average discount percentage applied |
| `avg_rating_180d` | Numeric | Average product rating given |
| `category_diversity_180d` | Numeric | Number of distinct categories purchased |

**Support Signals (3 features, 90-day window)**

| Feature | Type | Description |
|---|---|---|
| `ticket_count_90d` | Numeric | Number of support tickets raised |
| `negative_ticket_rate_90d` | Numeric | Proportion of tickets with negative sentiment |
| `avg_resolution_hours_90d` | Numeric | Average hours to resolve a support ticket |

**Web & App Engagement (7 features, 30-day window)**

| Feature | Type | Description |
|---|---|---|
| `sessions_30d` | Numeric | Number of app/web sessions |
| `product_views_30d` | Numeric | Number of product pages viewed |
| `cart_adds_30d` | Numeric | Number of items added to cart |
| `wishlist_adds_30d` | Numeric | Number of items added to wishlist |
| `abandoned_carts_30d` | Numeric | Number of carts not checked out |
| `last_visit_days_ago` | Numeric | Days since most recent app/web visit |
| `days_since_signup` | Numeric | Customer tenure in days |

**Campaign Engagement (2 features, 30-day window)**

| Feature | Type | Description |
|---|---|---|
| `email_opens_30d` | Numeric | Number of marketing emails opened |
| `campaign_clicks_30d` | Numeric | Number of campaign links clicked |

---

## 5. Leakage Prevention

Target leakage would occur if any information generated after the churn label observation window was used as a model input. The following measures were taken:

| Measure | Detail |
|---|---|
| Customer ID excluded | Not used as a feature |
| Snapshot date excluded | Not used as a feature |
| Split column excluded | Train/val/test indicator not used as a feature |
| Churn label used only as target | `churn_next_60d` not included in feature set |
| Feature windows pre-date snapshot | All behavioural windows (30d, 90d, 180d) reference history prior to snapshot |
| Preprocessing fitted on train only | StandardScaler and SimpleImputer fitted exclusively on `X_train` before transforming validation and test sets |

---

## 6. Model Development Process

### Step 1 — Data Preparation
All raw tables (customers, orders, tickets, web events, campaigns) were merged at the customer level using the `rfm_modeling_snapshot.csv` as the base feature table.

### Step 2 — Train / Validation / Test Split
The dataset was split using the pre-defined split column from the snapshot, resulting in 1,728 / 336 / 336 customers in train / validation / test sets respectively.

### Step 3 — Preprocessing Pipeline
A scikit-learn `Pipeline` was constructed with:
- **Numeric features:** `SimpleImputer(strategy='median')` → `StandardScaler()`
- **Categorical features:** `SimpleImputer(strategy='most_frequent')` → `OneHotEncoder(handle_unknown='ignore')`

All preprocessing was fitted on training data only and applied to validation and test data via `transform`.

### Step 4 — Baseline Model
Logistic Regression with default parameters was trained as the interpretable baseline.

### Step 5 — Advanced Models
Random Forest, Gradient Boosting, and XGBoost were trained as stronger alternatives.

### Step 6 — Hyperparameter Tuning
GridSearchCV with 5-fold cross-validation was used to tune each model, optimising for F1-Score.

| Model | Parameter Grid | Best Parameters | Best CV F1 |
|---|---|---|---|
| Logistic Regression | C ∈ {0.01, 0.1, 1, 10} | C = 1 | 0.7783 |
| Random Forest | n_estimators ∈ {100, 200}, max_depth ∈ {5, 10, 15}, min_samples_leaf ∈ {2, 5, 10} | n_estimators=200, max_depth=15, min_samples_leaf=2 | 0.7748 |

### Step 7 — Model Comparison and Selection
All tuned models were evaluated on the held-out validation set. Logistic Regression outperformed all alternatives on F1, ROC-AUC, and PR-AUC.

### Step 8 — Threshold Optimisation
Thresholds from 0.10 to 0.90 (step 0.05) were evaluated. The complete results:

| Threshold | Precision | Recall | F1 |
|---|---|---|---|
| 0.30 | 0.6919 | 0.8707 | 0.7711 |
| 0.35 | 0.7110 | 0.8367 | 0.7688 |
| 0.40 | 0.7455 | 0.8367 | 0.7885 |
| **0.45** | **0.7891** | **0.7891** | **0.7891** |
| 0.50 | 0.8058 | 0.7619 | 0.7832 |
| 0.55 | 0.8154 | 0.7211 | 0.7653 |
| 0.60 | 0.8333 | 0.6803 | 0.7491 |

**Selected threshold: 0.45** — highest F1 across all thresholds evaluated.

### Step 9 — Interpretability
Logistic regression coefficients were extracted and ranked by absolute value. SHAP (LinearExplainer) was applied to a 100-customer sample from the validation set to validate that coefficient-based importance aligned with SHAP attribution.

### Step 10 — Model Export
The final fitted pipeline (preprocessor + classifier) was serialised to `model.pkl` using `joblib`. The pipeline can be loaded and applied directly to raw feature data without manual preprocessing.

---

## 7. Performance Metrics

All metrics below are reported on the **validation set** (n=336) at the selected threshold of 0.45.

### Classification Metrics

| Metric | Score | Interpretation |
|---|---|---|
| Accuracy | **0.8155** | 81.6% of customers correctly classified |
| Precision | **0.7891** | 79% of predicted churners actually churned |
| Recall | **0.7891** | 79% of actual churners were correctly identified |
| F1 Score | **0.7891** | Harmonic mean of precision and recall — maximised at 0.45 |
| ROC-AUC | **0.8827** | Strong discriminative ability; random = 0.50, perfect = 1.00 |
| PR-AUC | **0.8676** | Strong performance on imbalanced positive-class identification |

### Confusion Matrix (Validation Set, Threshold = 0.45)

| | Predicted: No Churn | Predicted: Churn |
|---|---|---|
| **Actual: No Churn** | 158 (True Negatives) | 31 (False Positives) |
| **Actual: Churn** | 31 (False Negatives) | 116 (True Positives) |

### Business Translation of Metrics

- **116 customers** correctly identified as churners — eligible for retention campaign
- **158 customers** correctly identified as safe — no unnecessary spend
- **31 false positives** — customers incorrectly flagged; will receive retention outreach they don't need. Estimated cost: ₹6,200–₹24,800 per campaign cycle (at ₹200–₹800 per intervention)
- **31 false negatives** — churners the model missed; no intervention will be triggered. Estimated cost: ₹155,000–₹465,000 in lost LTV (at ₹5,000–₹15,000 per customer)

**The cost asymmetry is approximately 10:1 in favour of reducing false negatives.** Stakeholders requiring higher recall should consider lowering the threshold to 0.40, accepting ~11 additional false positives in exchange for recovering ~9 additional true positives.

---

## 8. Feature Importance

Feature importance was derived from standardised Logistic Regression coefficients. Positive coefficients increase predicted churn probability; negative coefficients decrease it.

### Top 20 Features by Importance

| Rank | Feature | Coefficient | Direction | Business Meaning |
|---|---|---|---|---|
| 1 | `recency_days` | +1.7258 | ↑ Risk | Longest gap since purchase is the dominant churn predictor |
| 2 | `monetary_180d` | −0.4338 | ↓ Risk | Higher total spend strongly signals loyalty |
| 3 | `preferred_category_Fragrance` | −0.3865 | ↓ Risk | Fragrance buyers churn less than average |
| 4 | `acquisition_channel_Organic` | −0.3812 | ↓ Risk | Organically acquired customers are stickier |
| 5 | `return_rate_180d` | +0.3446 | ↑ Risk | Higher return rate predicts dissatisfaction |
| 6 | `ticket_count_90d` | −0.3053 | ↓ Risk | ⚠️ See note below |
| 7 | `negative_ticket_rate_90d` | +0.3020 | ↑ Risk | Negative sentiment in support signals churn risk |
| 8 | `avg_discount_pct_180d` | +0.2938 | ↑ Risk | Heavy discounters may be price-sensitive or disloyal |
| 9 | `last_visit_days_ago` | +0.2880 | ↑ Risk | Longer time since last visit signals disengagement |
| 10 | `loyalty_tier_Platinum` | −0.2757 | ↓ Risk | Platinum members are significantly less likely to churn |
| 11 | `preferred_category_Baby Care` | +0.2646 | ↑ Risk | Baby Care buyers have above-average churn rates |
| 12 | `sessions_30d` | −0.2024 | ↓ Risk | Active browsers are lower risk |
| 13 | `preferred_category_Skin Care` | +0.1826 | ↑ Risk | Skin Care buyers churn slightly above average |
| 14 | `frequency_180d` | −0.1759 | ↓ Risk | Repeat purchasers are lower risk |
| 15 | `loyalty_tier_Silver` | +0.1729 | ↑ Risk | Silver-tier members churn more than Gold/Platinum |
| 16 | `campaign_clicks_30d` | −0.1704 | ↓ Risk | Clicking on campaigns signals active interest |
| 17 | `acquisition_channel_Google Search` | +0.1511 | ↑ Risk | Search-acquired customers churn slightly more |
| 18 | `preferred_category_Hair Care` | −0.1360 | ↓ Risk | Hair Care buyers churn less than average |
| 19 | `marketing_consent_Yes` | −0.1347 | ↓ Risk | Consenting customers are more reachable and retained |
| 20 | `category_diversity_180d` | +0.1332 | ↑ Risk | Counterintuitive — may indicate trial behaviour, not loyalty |

### Important Note on `ticket_count_90d` (Rank 6)

The negative coefficient (−0.31) on `ticket_count_90d` means the model treats "has support tickets" as a churn-risk reducer, which is counterintuitive. This is likely a confound: engaged customers who contact support may also be more invested in the product. However, this masks genuine service-failure cases where ticket count is high due to poor experience. Error analysis confirmed that at least one false negative (Customer Index 187) was missed partly because this coefficient suppressed its risk score. **Recommended action:** Separate `ticket_count_90d` from `negative_ticket_rate_90d` in the feature engineering phase, or add a `negative_ticket_count` absolute-count feature to decouple engagement from sentiment.

### Key Takeaways

- **Recency is by far the dominant feature** (coefficient 4× larger than the next feature). This means the model is very sensitive to purchase gap length. This creates both false positives (infrequent buyers with long cycles) and false negatives (recently purchased but already disengaged customers).
- **Spend and frequency are strong retention signals.** High-value, high-frequency customers score much lower risk automatically.
- **Organic and Referral acquisition channels** produce the most loyal customers. Instagram and Google Search customers are relatively higher risk.
- **Platinum loyalty dramatically reduces risk.** Silver loyalty slightly increases it compared to no loyalty tier.
- **Marketing consent** has a protective effect: customers who consent to marketing are more likely to be reachable and retained.

---

## 9. Threshold Selection Rationale

The default scikit-learn threshold of 0.50 was not used as the final decision boundary. A data-driven threshold optimisation was performed across the full 0.10–0.90 range.

**Selected threshold: 0.45**

### Why 0.45

- 0.45 produced the highest F1-Score (0.7891) across all evaluated thresholds
- At 0.45, Precision and Recall are exactly balanced (both 78.91%), which supports operational fairness between campaign cost and coverage
- Lowering to 0.40 marginally improves recall (to ~0.837) but reduces precision significantly (to ~0.746), increasing campaign waste by approximately 37% for a 5.8% improvement in churner capture

### Segment-Specific Threshold Recommendations

A single threshold may not be optimal for all customer segments. The following segment-specific strategy is recommended for future iterations:

| Segment | Recommended Threshold | Business Rationale |
|---|---|---|
| Gold / Platinum members | 0.35 | Higher LTV means false negative cost is greater; accept more FPs |
| Silver members | 0.45 | Current setting is well-calibrated for this segment |
| No loyalty tier assigned | 0.50 | Lower retention budget priority; restrict interventions to clearest cases |

---

## 10. Limitations

### Technical Limitations

| Limitation | Impact |
|---|---|
| Recency dominates all other features | Model may over-predict churn for infrequent-but-loyal buyers and under-predict for recently-active-but-disengaged customers |
| No abandoned-cart conversion rate feature | Customers who browse heavily but never convert are a high-risk group the model cannot distinguish from normal browsers |
| No "reachability" feature | Customers with `marketing_consent = No` and zero campaign engagement cannot be targeted even if correctly identified as high risk |
| Single-threshold classification | Segment differences in LTV and churn cost are not reflected in a single decision boundary |
| Snapshot-based features | Model is trained on a single point-in-time snapshot; it cannot capture rate-of-change (e.g., "sessions declining over 3 months") |
| External factors not included | Competitor promotions, seasonal demand shifts, macroeconomic changes are not captured |
| Training data window | Model reflects behaviour patterns during the training period only; if customer acquisition channel mix or product range changes, model may degrade |

### Known Model Weaknesses (from Error Analysis)

1. **Structural FN failure — recently purchased but unhappy customers:** A 0-day recency with a 1.0/5 rating produces a probability of only 19.6% because recency dominates. The model cannot detect "bought and immediately dissatisfied."

2. **Structural FN failure — disengaged customers with clean purchase history:** High-spend, Gold-tier customers with a poor support experience can score as low as 9.8% because monetary value and frequency coefficients suppress the risk score.

3. **Structural FP failure — infrequent buyers with long repurchase cycles:** 100+ day recency gaps for high-value customers (₹1,800+ spend) are read as churn risk even when the customer is simply on a longer purchase cycle.

4. **Counterintuitive ticket_count coefficient:** Discussed in Section 8 above.

---

## 11. Ethical Considerations

### Potential Risks

| Risk | Description | Mitigation |
|---|---|---|
| Over-targeting | Customers repeatedly receiving "we don't want to lose you" messaging may feel surveilled or patronised, potentially reducing satisfaction rather than increasing retention | Cap outreach frequency; do not contact a customer more than once per 30-day period based solely on model score |
| Discount dependency | Offering discounts to high-scoring customers conditions them to expect discounts before each purchase, eroding margins over time | Test retention offers without discounts (e.g., exclusive content, early access) before defaulting to price reduction |
| Acquisition channel bias | Customers from organic and referral channels score systematically lower risk. This is a legitimate signal, but if retention budget is allocated purely by score, Instagram and Google Search-acquired customers may receive disproportionately less service | Review retention resource allocation by acquisition channel quarterly |
| Age group differential impact | Model uses age group as a feature. If certain age groups (e.g., 45+) have systematically higher predicted churn rates due to lower digital engagement rather than genuine disengagement intent, older customers may receive unwanted outreach more frequently | Monitor false positive rates by age group; adjust if disparity exceeds 15 percentage points across groups |
| No causal inference | Correlation is not causation. A customer who clicks more campaigns does not churn less because of campaign clicks — both may be caused by a third factor (genuine product satisfaction). Acting as if campaign clicks cause retention could lead to over-investment in campaign frequency | Use model for targeting, not for causal attribution in marketing mix analysis |
| Automated decision risk | If churn scores trigger fully automated actions (e.g., auto-applied discounts, auto-suppressed communications), errors will not be caught before they affect customers | Require human review for all interventions above a minimum spend threshold |

### Fairness Assessment Recommendation

Before deployment at scale, compute the following metrics across demographic subgroups:

- False positive rate by `age_group`
- False positive rate by `city_tier`
- False negative rate by `loyalty_tier`

If any subgroup shows a false positive rate more than 15 percentage points above the overall rate (31/189 = 16.4%), investigate whether the feature set is capturing genuine behaviour or proxying for a demographic characteristic.

---

## 12. Monitoring Recommendations

### Metrics to Track in Production

| Metric | Monitoring Frequency | Alert Threshold |
|---|---|---|
| Precision on flagged customers | Monthly | Drop below 0.70 |
| Recall (estimated via sample audit) | Quarterly | Drop below 0.70 |
| ROC-AUC | Monthly | Drop below 0.83 |
| Mean predicted churn probability | Weekly | Change of >5 percentage points vs. 4-week average |
| Churn rate in predicted non-churn population | Monthly | Exceeds 15% |
| Feature distribution drift (recency, sessions, monetary) | Monthly | KS statistic > 0.10 vs. training distribution |
| Campaign conversion rate for flagged customers | Monthly | Drop below 15% suggests model is targeting the wrong customers |

### Retraining Triggers

Retrain the model if **any one** of the following conditions is met:

- Validation ROC-AUC drops below 0.83 in any monthly evaluation
- Mean predicted probability shifts by more than 5 percentage points from baseline
- A major product launch, pricing change, or acquisition channel strategy shift occurs
- Customer demographic mix changes materially (>10% shift in any segment)
- Scheduled retraining: every 4 months regardless of performance

### Retraining Protocol

1. Refresh training data to include the most recent 12-month window
2. Re-run the full preprocessing pipeline (refit scaler and encoder on new training data)
3. Re-evaluate all four model types on the new validation set before committing to the same architecture
4. Re-optimise threshold on the new validation set before production deployment
5. Document all metric changes between the old and new model versions

---

## 13. When Not to Use This Model

| Scenario | Reason |
|---|---|
| As the sole basis for terminating a customer relationship or service | Model errors are inevitable; irreversible decisions require human review |
| For legal, compliance, or financial decisions | Model was trained on behavioural signals, not financial risk |
| To evaluate absolute customer value | This model predicts churn probability, not customer LTV or profitability |
| When the customer base has undergone a major structural change since training | If the brand has launched a new product line, changed pricing, or acquired a new customer segment, the model's learned patterns may not generalise |
| For customers active for fewer than 30 days | New customers have insufficient behavioural history; predictions will be unreliable |
| As a real-time scoring system | The model is designed for batch scoring on a periodic snapshot; real-time deployment would require re-engineering the feature pipeline |

---

## 14. Deployment Notes

### Loading the Model

```python
import joblib
import pandas as pd

model = joblib.load("model.pkl")

# model is a full scikit-learn Pipeline (preprocessor + classifier)
# Input: raw feature dataframe with the 25 original features
# Output: churn probability via predict_proba, or binary prediction via predict

churn_proba = model.predict_proba(X_new)[:, 1]
churn_flag = (churn_proba >= 0.45).astype(int)
```

### Required Input Schema

The model expects a DataFrame with the following 25 columns (matching training feature names exactly):

`city_tier`, `age_group`, `acquisition_channel`, `loyalty_tier`, `preferred_category`, `marketing_consent`, `recency_days`, `frequency_180d`, `monetary_180d`, `return_rate_180d`, `avg_discount_pct_180d`, `avg_rating_180d`, `category_diversity_180d`, `ticket_count_90d`, `negative_ticket_rate_90d`, `avg_resolution_hours_90d`, `days_since_signup`, `sessions_30d`, `product_views_30d`, `cart_adds_30d`, `wishlist_adds_30d`, `abandoned_carts_30d`, `email_opens_30d`, `campaign_clicks_30d`, `last_visit_days_ago`

Missing values in numeric columns will be imputed with training-set medians (handled automatically by the pipeline).

---

## 15. Conclusion

The Logistic Regression churn prediction model achieves strong discriminative performance (ROC-AUC 0.8827, F1 0.7891) on the validation set and outperforms all evaluated ensemble alternatives. It provides an interpretable, production-ready solution for identifying customers at elevated churn risk within a 60-day window.

The model's primary strength is identifying the combination of purchase inactivity, spending decline, support dissatisfaction, and digital disengagement that precedes customer exit. Its primary limitation is an over-reliance on recency, which creates predictable blind spots for recently-purchased but already-disengaged customers.

Five feature engineering improvements have been identified from error analysis that are expected to push the next model version toward ROC-AUC of 0.90+. Segment-specific thresholding for Gold and Platinum customers is recommended as a near-term operational improvement that requires no retraining.

This model should be treated as a first-generation production tool. It provides immediate business value and should be used, but its outputs should always be reviewed by a human before high-stakes retention decisions are made.