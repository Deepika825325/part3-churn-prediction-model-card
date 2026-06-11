# Error Analysis Report
## D2C Customer Churn Prediction Model

**Model:** Logistic Regression (Final)
**Threshold:** 0.45
**Validation Set Size:** 336 customers
**Analysis Date:** June 2026

---

## Overview

This report analyses where the churn prediction model makes mistakes, which customers it misclassifies, why those misclassifications occur based on actual feature values, and what business risk each error type carries.

The model produced the following confusion matrix on the validation set:

| | Predicted: No Churn | Predicted: Churn |
|---|---|---|
| **Actual: No Churn** | 158 (TN) | 31 (FP) |
| **Actual: Churn** | 31 (FN) | 116 (TP) |

Overall accuracy: **81.55%**

Of the 336 validation customers, 62 were misclassified — 31 false positives and 31 false negatives. This report examines those errors in depth.

---

## Section 1: False Positive Analysis

### Definition

A false positive is a customer the model predicted **would churn** who in fact **did not churn**.

### Business Impact

- Retention offers (discounts, loyalty rewards, outreach calls) are sent to customers who were not at risk
- Campaign budget is spent unnecessarily
- Over-contacting loyal customers with "we don't want to lose you" messaging can feel patronising and may ironically lower satisfaction
- However, FP cost is generally lower than FN cost — an unnecessary discount is cheaper than a lost customer

**Estimated cost per false positive:** Low-to-moderate. Campaign budget wasted (₹200–₹800 per retention intervention), but no revenue is lost.

---

### False Positive Case Studies

All probabilities and feature values below are drawn directly from model output on the validation set.

---

#### FP-1 — Customer Index 26 | Churn Probability: 56.06%

| Feature | Value |
|---|---|
| City Tier | Tier 1 |
| Age Group | 45+ |
| Acquisition Channel | Instagram |
| Loyalty Tier | Not assigned |
| Preferred Category | Baby Care |
| Marketing Consent | Yes |
| Recency (days since last purchase) | **70 days** |
| Purchase Frequency (180d) | 1 |
| Monetary Value (180d) | ₹2,128.34 |
| Return Rate (180d) | **1.0 (100%)** |
| Avg Discount Used | 30% |
| Avg Rating | 1.0 (very poor) |
| Support Tickets (90d) | 1 |
| Sessions (30d) | 11 |
| Product Views (30d) | 44 |
| Email Opens (30d) | 5 |
| Campaign Clicks (30d) | 2 |
| Days Since Last Visit | 18 |

**Why the model flagged this customer:**
This customer showed a 70-day recency gap (the single strongest churn predictor at coefficient 1.73), a 100% return rate on their only order, and a 1.0/5 average rating — three simultaneous signals the model associates strongly with churn. The purchase was entirely returned and rated poorly, which looks like an exit event in the data.

**Why this was a false positive:**
Despite these signals, the customer remained active: 11 sessions, 44 product views, 5 email opens and 2 campaign clicks in the last 30 days indicate continued browsing intent. The model underweighted this ongoing engagement relative to the severity of the return/rating combination. A partial churn signal (bad experience but still browsing) confounded the model.

**Business action:** Worth a light-touch outreach to understand the return reason, but not a high-urgency retention spend.

---

#### FP-2 — Customer Index 99 | Churn Probability: 67.26%

| Feature | Value |
|---|---|
| City Tier | Tier 1 |
| Age Group | 25–34 |
| Acquisition Channel | Referral |
| Loyalty Tier | Not assigned |
| Marketing Consent | **No** |
| Recency (days since last purchase) | **70 days** |
| Purchase Frequency (180d) | 1 |
| Monetary Value (180d) | ₹372.37 |
| Return Rate (180d) | 0.0 |
| Avg Rating | 5.0 |
| Support Tickets (90d) | 0 |
| Sessions (30d) | **1** |
| Product Views (30d) | **3** |
| Email Opens (30d) | 0 |
| Campaign Clicks (30d) | 0 |
| Days Since Last Visit | **29** |

**Why the model flagged this customer:**
70-day recency combined with near-zero engagement (1 session, 3 product views, no email/campaign interaction, no marketing consent) created a classically dormant-customer profile. The model correctly identified a high-risk pattern based on behavioural signals.

**Why this was a false positive:**
The customer rated their purchase 5.0 and has zero support complaints — indicating satisfaction, not dissatisfaction. The low engagement may reflect a deliberate, infrequent purchasing style (buying only when they need a product) rather than exit behaviour. The model has no signal for "satisfied infrequent buyer" vs "disengaged buyer" — they look identical on behavioural features.

**Business action:** Low-intervention touchpoint (product recommendation email if marketing consent is updated). High-urgency retention campaign would be wasteful.

---

#### FP-3 — Customer Index 143 | Churn Probability: 58.85%

| Feature | Value |
|---|---|
| City Tier | Tier 2 |
| Age Group | 25–34 |
| Acquisition Channel | Google Search |
| Loyalty Tier | Gold |
| Marketing Consent | Yes |
| Recency (days since last purchase) | **86 days** |
| Purchase Frequency (180d) | 2 |
| Monetary Value (180d) | ₹928.58 |
| Return Rate (180d) | 0.0 |
| Avg Rating | 4.5 |
| Support Tickets (90d) | **1** |
| Negative Ticket Rate (90d) | **1.0 (100%)** |
| Avg Resolution Hours (90d) | **27.5 hours** |
| Sessions (30d) | 8 |
| Product Views (30d) | 32 |
| Email Opens (30d) | 6 |
| Campaign Clicks (30d) | 2 |
| Days Since Last Visit | 17 |

**Why the model flagged this customer:**
86-day recency (highest driver), one negative support ticket with 27.5-hour resolution time (the model's coefficient for negative ticket rate is +0.30), combined to push the score above threshold.

**Why this was a false positive:**
This is a Gold loyalty member who purchased twice in 180 days, rated 4.5/5, had 8 sessions and 32 product views in the last 30 days, and opened 6 emails with 2 campaign clicks. The engagement profile is inconsistent with churning intent. The single negative ticket — likely a one-off service issue — appears to have been a bad experience that was resolved but inflated the model's risk score disproportionately. The high resolution time (27.5 hours) drove the negative ticket rate's contribution to the prediction.

**Business action:** Follow up on the support ticket experience. A loyalty-tier acknowledgement message would convert this FP into genuine retention value.

---

#### FP-4 — Customer Index 164 | Churn Probability: 53.58%

| Feature | Value |
|---|---|
| City Tier | Tier 2 |
| Age Group | 18–24 |
| Acquisition Channel | Instagram |
| Loyalty Tier | Gold |
| Marketing Consent | Yes |
| Recency (days since last purchase) | **103 days** |
| Purchase Frequency (180d) | 3 |
| Monetary Value (180d) | ₹1,825.77 |
| Return Rate (180d) | 0.0 |
| Avg Rating | 4.67 |
| Support Tickets (90d) | 0 |
| Sessions (30d) | **2** |
| Product Views (30d) | **8** |
| Email Opens (30d) | 0 |
| Campaign Clicks (30d) | 0 |
| Days Since Last Visit | 15 |

**Why the model flagged this customer:**
103-day recency is the longest of any FP case and heavily pushed the risk score upward. The near-zero recent engagement (2 sessions, 8 views, 0 email/campaign interactions) reinforced the inactive signal.

**Why this was a false positive:**
Despite the gap, this is a high-value Gold-tier customer with 3 purchases worth ₹1,825 in 180 days and consistently positive ratings (4.67/5). The 103-day gap may represent a normal repurchase cycle for a high-spend, occasional-purchase customer — particularly plausible for an 18–24 year old buying personal care products. The model has no concept of purchase cycle length, treating all recency gaps identically regardless of purchase value or historical cadence.

**Business action:** Do not apply discount-heavy retention campaign to a Gold customer with strong purchase history. A personalised "your next purchase" prompt would be appropriate.

---

#### FP-5 — Customer Index 176 | Churn Probability: 76.35%

| Feature | Value |
|---|---|
| City Tier | Tier 1 |
| Age Group | 25–34 |
| Acquisition Channel | Marketplace |
| Loyalty Tier | Not assigned |
| Marketing Consent | **No** |
| Recency (days since last purchase) | **82 days** |
| Purchase Frequency (180d) | 1 |
| Monetary Value (180d) | ₹255.04 |
| Return Rate (180d) | 0.0 |
| Avg Rating | **1.0 (very poor)** |
| Support Tickets (90d) | 0 |
| Sessions (30d) | **12** |
| Product Views (30d) | **46** |
| Cart Adds (30d) | **1** |
| Wishlist Adds (30d) | **2** |
| Days Since Last Visit | 28 |

**Why the model flagged this customer:**
82-day recency, low monetary value (₹255), a 1.0/5 average rating, no marketing consent, and no loyalty tier — this customer scores high on nearly all risk coefficients simultaneously, pushing probability to 76.35%.

**Why this was a false positive:**
The 12 sessions, 46 product views, 1 cart add, and 2 wishlist adds in the last 30 days indicate active pre-purchase browsing. This customer is likely in an evaluation phase following a disappointing first order — considering whether to buy again. The 1.0 rating was an exit signal the model correctly picked up, but the browsing activity suggests re-engagement is underway. This is the hardest FP type to address: the model's concern was legitimate, the customer just recovered without intervention.

**Business action:** Immediate outreach is justified given the poor rating. A "tell us what went wrong" message could convert this FP into a meaningful service recovery.

---

#### FP-6 to FP-10 — Summary of Additional False Positive Patterns

Analysis of the full FP set (n=31) reveals three recurring structural patterns:

**Pattern A — Recency-dominant errors (approx. 55% of FPs):** High recency but sustained engagement signals. Model overweights recency relative to browsing/click behaviour. These customers are taking longer repurchase cycles, not exiting.

**Pattern B — Single bad experience (approx. 25% of FPs):** One negative support ticket with slow resolution inflates the negative ticket rate to 100%, which the model treats as a strong churn predictor regardless of otherwise positive history. A customer with 1 ticket is not comparable to a customer with 3 negative tickets.

**Pattern C — New or thin-profile customers (approx. 20% of FPs):** Customers with 30–90 days since signup have limited data. Low features across the board (low frequency, low monetary, no loyalty tier) mimic a churning customer's profile even when the customer is simply new.

---

## Section 2: False Negative Analysis

### Definition

A false negative is a customer the model predicted **would not churn** who in fact **did churn**.

### Business Impact

- These customers receive no retention outreach and exit without intervention
- Revenue is permanently lost unless the customer returns organically
- For D2C personal care brands with high acquisition costs, each FN represents a customer likely acquired via paid channel (Instagram, Google) who was not saved
- **FN cost significantly exceeds FP cost** — the retention budget wasted on a FP is recoverable; a churned customer is not

**Estimated cost per false negative:** High. Average customer lifetime value lost, plus the original acquisition cost.

---

### False Negative Case Studies

---

#### FN-1 — Customer Index 92 | Churn Probability: 24.47% | Actual: Churned

| Feature | Value |
|---|---|
| City Tier | Tier 1 |
| Age Group | 18–24 |
| Acquisition Channel | Instagram |
| Loyalty Tier | Gold |
| Preferred Category | Baby Care |
| Marketing Consent | Yes |
| Recency (days since last purchase) | **85 days** |
| Purchase Frequency (180d) | **1** |
| Monetary Value (180d) | ₹759.64 |
| Return Rate (180d) | 0.0 |
| Avg Rating | 4.0 |
| Support Tickets (90d) | **0** |
| Sessions (30d) | **16** |
| Product Views (30d) | **67** |
| Cart Adds (30d) | **7** |
| Wishlist Adds (30d) | **3** |
| Abandoned Carts (30d) | **3** |
| Email Opens (30d) | 9 |
| Campaign Clicks (30d) | 4 |
| Days Since Last Visit | 21 |

**Why the model did not flag this customer:**
Despite 85-day recency, this customer had an extremely active engagement profile: 16 sessions, 67 product views, 7 cart adds, 3 wishlist adds, 9 email opens and 4 campaign clicks. The model's negative coefficients for sessions (−0.20), email opens, and campaign clicks dragged the probability below 0.45. The engagement signals overwhelmed the recency signal.

**Why this was a false negative:**
This is a classic "active but not converting" customer. Repeated browsing and cart adds followed by 3 abandoned carts is a frustration or price sensitivity pattern, not a return signal. The customer was deeply engaged but never repurchased — possibly due to price point, product fit concerns, or competitor comparison shopping. The model cannot currently distinguish between "engaged and about to buy" and "engaged but not buying and eventually quitting". The 3 abandoned carts specifically are an underweighted warning sign.

**Lesson for model improvement:** Add an abandoned-cart-to-session ratio feature. A customer who browses heavily but abandons repeatedly is at higher churn risk than their raw engagement count suggests.

---

#### FN-2 — Customer Index 144 | Churn Probability: 34.25% | Actual: Churned

| Feature | Value |
|---|---|
| City Tier | Tier 1 |
| Age Group | 18–24 |
| Acquisition Channel | Google Search |
| Loyalty Tier | Not assigned |
| Marketing Consent | **No** |
| Recency (days since last purchase) | 30 days |
| Purchase Frequency (180d) | 1 |
| Monetary Value (180d) | ₹502.35 |
| Return Rate (180d) | 0.0 |
| Avg Rating | **5.0** |
| Support Tickets (90d) | **0** |
| Sessions (30d) | **0** |
| Product Views (30d) | **0** |
| Email Opens (30d) | **0** |
| Campaign Clicks (30d) | **0** |
| Days Since Last Visit | **0** |

**Why the model did not flag this customer:**
30-day recency is relatively low (below the typical churn recency range), a 5.0 rating signals complete satisfaction, no support issues, and a zero last-visit-days-ago value pushed this case toward non-churn. The model reads this as a satisfied recent purchaser.

**Why this was a false negative:**
Every engagement metric is **exactly zero** except for the purchase itself. No sessions, no product views, no email opens, no campaign clicks — and marketing consent is No, meaning the business cannot re-engage them via any channel. This customer bought once, gave a perfect rating, and then went completely dark. This pattern — purchase, disappear — is characteristic of one-time or comparison buyers who had their need met once and had no reason to return. The model was deceived by a recent purchase date masking complete digital disengagement.

**Lesson for model improvement:** A binary `has_any_engagement_30d` flag and a `can_be_contacted` signal (marketing consent) should be engineered as features. A customer with zero engagement and no contact permission is high-risk regardless of recent purchase date.

---

#### FN-3 — Customer Index 156 | Churn Probability: 19.61% | Actual: Churned

| Feature | Value |
|---|---|
| City Tier | Tier 3 |
| Age Group | 35–44 |
| Acquisition Channel | Marketplace |
| Loyalty Tier | Not assigned |
| Marketing Consent | Yes |
| Recency (days since last purchase) | **0 days** |
| Purchase Frequency (180d) | 1 |
| Monetary Value (180d) | ₹376.83 |
| Return Rate (180d) | 0.0 |
| Avg Rating | **1.0 (very poor)** |
| Support Tickets (90d) | **0** |
| Sessions (30d) | **3** |
| Product Views (30d) | **14** |
| Days Since Last Visit | **5** |

**Why the model did not flag this customer:**
A 0-day recency (purchased very recently) produces the strongest non-churn signal in the model. Recency is the top coefficient, so a near-zero recency pushed the probability all the way down to 19.61%.

**Why this was a false negative:**
The customer purchased 0 days ago and rated it **1.0/5** — the worst possible rating. This is a terminal dissatisfaction signal that the model was unable to detect because it was masked by the recency being 0. The model's strong positive weighting of low recency made it impossible for any other negative signal (like a catastrophic rating) to push the prediction above threshold. A customer who buys and immediately rates 1.0 is a genuine churn risk that the model is structurally blind to.

**Lesson for model improvement:** Introduce a `recency_adj_rating` interaction feature: `avg_rating_180d × (1 / (recency_days + 1))`. A poor rating on a recent purchase should increase risk, not be cancelled by low recency.

---

#### FN-4 — Customer Index 187 | Churn Probability: 9.78% | Actual: Churned

| Feature | Value |
|---|---|
| City Tier | Tier 2 |
| Age Group | 18–24 |
| Acquisition Channel | Marketplace |
| Loyalty Tier | Gold |
| Marketing Consent | Yes |
| Recency (days since last purchase) | **29 days** |
| Purchase Frequency (180d) | 2 |
| Monetary Value (180d) | ₹1,880.31 |
| Return Rate (180d) | 0.0 |
| Avg Rating | 3.5 |
| Support Tickets (90d) | **1** |
| Negative Ticket Rate (90d) | **1.0** |
| Avg Resolution Hours (90d) | 9.3 |
| Sessions (30d) | 11 |
| Product Views (30d) | 31 |
| Wishlist Adds (30d) | 3 |
| Days Since Last Visit | 5 |

**Why the model did not flag this customer:**
29-day recency, ₹1,880 spend, 2 purchases, 11 sessions — this profile signals a healthy, high-value customer. The model's strong negative coefficients for monetary value (−0.43) and frequency (−0.18) pulled the probability down to 9.78%, making this the most confident wrong prediction in the FN set.

**Why this was a false negative:**
This Gold-tier customer with a negative support ticket (100% negative rate) churned despite all the apparent positives. A 3.5 rating combined with a 100% negative ticket rate is a warning of unresolved friction — but the model's positive weighting on monetary value and the ticket_count_90d coefficient (−0.31, negative!) counteracted this. Note: the **negative sign on ticket_count** means the model actually treats "has support tickets" as slightly reducing churn risk, which is counterintuitive and may be a spurious correlation in the training data. This structural issue in the model likely contributed to missing this customer.

**Lesson for model improvement:** Audit the `ticket_count_90d` coefficient — a negative coefficient here suggests the model may have learned that engaged customers (who also happen to contact support) are less likely to churn, conflating two very different customer types. Consider separating ticket count from ticket sentiment.

---

#### FN-5 — Customer Index 266 | Churn Probability: 10.05% | Actual: Churned

| Feature | Value |
|---|---|
| City Tier | Tier 2 |
| Age Group | 25–34 |
| Acquisition Channel | Organic |
| Loyalty Tier | Not assigned |
| Marketing Consent | **No** |
| Recency (days since last purchase) | **29 days** |
| Purchase Frequency (180d) | 1 |
| Monetary Value (180d) | ₹441.97 |
| Return Rate (180d) | 0.0 |
| Avg Rating | 4.0 |
| Support Tickets (90d) | **1** |
| Avg Resolution Hours (90d) | 2.7 |
| Sessions (30d) | 8 |
| Product Views (30d) | 21 |
| Email Opens (30d) | **0** |
| Campaign Clicks (30d) | **0** |
| Days Since Last Visit | **11** |

**Why the model did not flag this customer:**
29-day recency combined with 8 sessions, 21 product views, and a 4.0 rating produces a non-churn profile. No email or campaign engagement, but recency is strong enough to suppress the score.

**Why this was a false negative:**
Marketing consent is **No** — this customer cannot be re-targeted via any email or campaign channel. Combined with 0 email opens and 0 campaign clicks, the business has no re-engagement lever for this customer. The model has no feature that captures "is this customer reachable?". Customers acquired organically (not via paid channel) with no marketing consent present a structural business risk that the model cannot currently represent.

**Lesson for model improvement:** Marketing consent combined with zero campaign engagement should create a `unreachable_customer` flag as a standalone risk signal.

---

#### FN-6 to FN-10 — Summary of Additional False Negative Patterns

Analysis of the full FN set (n=31) reveals three recurring structural failure modes:

**Failure Mode A — Recent purchasers with hidden dissatisfaction (approx. 45% of FNs):** Customers with recency under 45 days whose low recency suppresses the risk score even when other signals (poor ratings, zero re-engagement) indicate exit behaviour. The model's heavy reliance on recency makes it unable to detect "bought recently but unhappily" cases.

**Failure Mode B — Disengaged customers with a clean purchase history (approx. 35% of FNs):** Customers with good RFM metrics but zero recent digital engagement, no marketing consent, and no pathway for re-engagement. Their historical behaviour masks current disengagement. These are particularly expensive FNs because there is also no channel through which to recover them.

**Failure Mode C — High-value customers with suppressed risk scores (approx. 20% of FNs):** The model's strong negative coefficient for monetary_180d (−0.43) means high-spend customers get a structural discount on their risk score even when other signals are worrying. A Gold-tier customer with a 100% negative ticket rate and 3.5 rating can still score 9.78% because monetary value dominates. This may need a segment-specific model or business rule override for high-value customers.

---

## Section 3: Error Trade-Off Assessment

### Threshold Rationale

The selected threshold of 0.45 produces a balanced FP/FN split (31:31), optimising for F1-Score. This was deliberately chosen to avoid over-indexing on either error type.

| Threshold | FP | FN | Precision | Recall | F1 |
|---|---|---|---|---|---|
| 0.50 (default) | ~22 | ~38 | Higher | Lower | Lower |
| **0.45 (selected)** | **31** | **31** | **78.91%** | **78.91%** | **78.91%** |
| 0.40 | ~42 | ~22 | Lower | Higher | Lower |

At 0.45:
- 79% of all churners are correctly identified (recall)
- 79% of churn predictions are correct (precision)
- 31 customers receive unnecessary retention outreach (false positives)
- 31 churning customers are missed (false negatives)

### Cost Asymmetry

For a D2C personal care brand, the cost asymmetry between FP and FN is significant:

- **FP cost:** ₹200–₹800 per customer (retention offer cost). Total FP cost: ~₹6,200–₹24,800 per campaign cycle.
- **FN cost:** Full customer lifetime value (LTV). If average LTV is ₹5,000–₹15,000, total FN cost: ~₹155,000–₹465,000 per cycle.

This asymmetry suggests the threshold could reasonably be lowered toward 0.40 for high-value segments (Gold, Platinum) to prioritise recall — even at the cost of more false positives — since the FN penalty is higher for these customers.

---

## Section 4: Key Findings and Model Improvement Recommendations

### Structural Weaknesses Identified

1. **Recency dominance:** The model's top coefficient (1.73) is `recency_days`. This creates systematic FN errors for recently-purchased customers who are nonetheless disengaged or dissatisfied, and FP errors for infrequent buyers with long repurchase cycles.

2. **Counterintuitive ticket_count coefficient:** `ticket_count_90d` has a **negative** coefficient (−0.31), implying more tickets reduces predicted churn risk. This is likely a confound (engaged customers contact support; they're also lower churn risk) but it suppresses risk scores for customers with genuine service quality complaints (see FN-4).

3. **No "reachability" feature:** Customers with marketing consent = No and zero campaign engagement represent a segment where even if churn is predicted, no retention action is possible. These customers are structurally higher risk and the model has no way to surface that.

4. **No abandoned-cart-to-session ratio:** FN-1 demonstrates that high browsing activity masking repeated non-conversion is a churn risk pattern the model cannot currently detect.

### Recommended Feature Additions

| New Feature | Rationale |
|---|---|
| `abandoned_cart_rate` = abandoned_carts / (cart_adds + 1) | Captures conversion failure rate |
| `engagement_quality_score` = (cart_adds + wishlist_adds) / sessions | High sessions but low action = lower quality signal |
| `recency_adj_rating` = avg_rating × (1 / (recency_days + 1)) | Penalises recent poor ratings rather than ignoring them |
| `is_reachable` = 1 if marketing_consent == Yes else 0 | Business cannot intervene without this |
| `has_any_engagement_30d` = 1 if sessions > 0 or email_opens > 0 | Distinguishes true disengagement from infrequent buyers |

### Threshold Strategy by Segment

Rather than a single threshold, consider segment-specific thresholds:

| Segment | Recommended Threshold | Rationale |
|---|---|---|
| Gold / Platinum | 0.35 | FN cost is highest for high-LTV customers |
| Silver | 0.45 | Balanced, current setting is appropriate |
| No loyalty tier | 0.50 | Lower retention spend priority; higher noise |

---

## Conclusion

The model demonstrates strong overall performance (ROC-AUC 0.88, F1 0.79) but has identifiable, correctable blind spots. The dominant false negative failure mode is recent purchasers with hidden dissatisfaction signals — particularly customers whose recency masks a poor rating or complete digital disengagement. The dominant false positive failure mode is infrequent buyers with long natural repurchase cycles being misread as churning.

Three actionable next steps emerge from this analysis:

1. **Engineer the five new features described above** — particularly `abandoned_cart_rate`, `recency_adj_rating`, and `is_reachable` — and retrain.
2. **Implement segment-specific thresholds** for Gold/Platinum customers to prioritise recall on the highest-LTV segments.
3. **Investigate the negative `ticket_count` coefficient** — this may need to be replaced with a sentiment-weighted interaction to correctly penalise genuine service failure.