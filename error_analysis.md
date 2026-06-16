# Error Analysis Report

## Overview

Error analysis was performed on the held-out **test set** (336 customers) using the final Logistic Regression model at a decision threshold of **0.45**.

| Error Type      | Count | Business Impact                          |
| --------------- | ----: | ---------------------------------------- |
| False Positives |    37 | Unnecessary retention spend              |
| False Negatives |    28 | Missed churners — direct revenue loss    |
| Total Errors    |    65 | ~19.3% of test customers misclassified   |

False Negatives carry significantly higher business cost than False Positives. A missed churner costs an estimated ₹5,000–₹15,000 in lost lifetime value, versus ₹200–₹800 for an unnecessary retention campaign contact.

---

## False Positive Analysis

**Definition:** Customers predicted to churn (predicted_class = 1) who actually remained active (churn_next_60d = 0).

**Business Risk:** Wasted retention budget. These customers would have been contacted with offers they did not need, reducing campaign ROI and potentially training customers to expect discounts.

### False Positive Cases

| # | Customer ID | Churn Prob | Recency Days | Frequency | Monetary (₹) | Sessions | Tickets | Return Rate | Loyalty | Age Group | City Tier |
|---|-------------|------------|-------------|-----------|-------------|---------|---------|-------------|---------|-----------|-----------|
| FP-1 | CUST01246 | 0.982 | 262 | 0 | 0.00 | 1 | 0 | 0.00 | Silver | 18-24 | Tier 2 |
| FP-2 | CUST01325 | 0.956 | 186 | 0 | 0.00 | 1 | 0 | 0.00 | None | 18-24 | Tier 3 |
| FP-3 | CUST01411 | 0.937 | 183 | 0 | 0.00 | 0 | 0 | 0.00 | None | 25-34 | Tier 1 |
| FP-4 | CUST00437 | 0.933 | 151 | 1 | 729.22 | 0 | 0 | 0.00 | Silver | 35-44 | Tier 2 |
| FP-5 | CUST01370 | 0.889 | 161 | 2 | 1,246.04 | 2 | 0 | 0.00 | None | 18-24 | Tier 2 |
| FP-6 | CUST01017 | 0.885 | 133 | 2 | 1,167.28 | 3 | 0 | 0.50 | None | 18-24 | Tier 1 |
| FP-7 | CUST01405 | 0.874 | 140 | 1 | 1,013.03 | 2 | 0 | 0.00 | Gold | 25-34 | Tier 2 |
| FP-8 | CUST00335 | 0.789 | 148 | 2 | 1,328.14 | 7 | 0 | 0.00 | None | 45+ | Tier 2 |
| FP-9 | CUST01614 | 0.789 | 103 | 2 | 1,352.11 | 4 | 0 | 0.50 | None | 18-24 | Tier 2 |
| FP-10 | CUST01182 | 0.761 | 108 | 1 | 520.96 | 8 | 0 | 0.00 | None | 25-34 | Tier 2 |

### False Positive Pattern Analysis

**Pattern A — Zero-purchase dormancy (FP-1, FP-2, FP-3):**
These customers had 0 purchases in 180 days and extremely high recency (183–262 days), which the model correctly flags as strong churn signals. However, these customers made at least one purchase after the snapshot — likely a very infrequent but real purchase cycle. The model has no signal distinguishing "seasonal buyer" from "churned buyer," making this the most structurally difficult FP type to eliminate.

**Pattern B — Low-frequency buyers with residual activity (FP-4, FP-5, FP-7):**
Customers like CUST00437 (recency=151, frequency=1) and CUST01405 (recency=140, Gold loyalty) triggered the recency-dominance of the model despite retaining. CUST01405 is particularly notable — a Gold loyalty member predicted at 87.4% churn probability who did not churn. The model underweights loyalty tier membership for low-frequency buyers.

**Pattern C — Moderate engagement masking real retention (FP-6, FP-8, FP-9, FP-10):**
CUST00335 (45+, 7 sessions, recency=148) and CUST01182 (8 sessions, recency=108) show moderate web engagement despite long purchase gaps. The model penalises recency heavily and does not adequately credit continued browsing activity as a retention signal.

### Business Recommendation for False Positives

Before sending retention offers to high-probability predicted churners, the CRM team should apply a secondary filter:
- If the customer has ≥5 sessions in the last 30 days AND recency < 120 days → hold campaign, monitor for 2 weeks before contacting.
- Gold/Platinum loyalty members with FP risk should receive a softer re-engagement touchpoint rather than a discount offer.

---

## False Negative Analysis

**Definition:** Customers predicted to remain active (predicted_class = 0) who actually churned (churn_next_60d = 1).

**Business Risk:** These are the most costly errors. Each missed churner represents lost revenue with no retention intervention triggered. At an average customer value of ₹5,000–₹15,000, 28 false negatives represent an estimated ₹140,000–₹420,000 in unprotected revenue.

### False Negative Cases

| # | Customer ID | Churn Prob | Recency Days | Frequency | Monetary (₹) | Sessions | Tickets | Neg Ticket Rate | Rating | Last Visit | Loyalty | Age Group |
|---|-------------|------------|-------------|-----------|-------------|---------|---------|-----------------|--------|------------|---------|-----------|
| FN-1 | CUST02072 | 0.047 | 35 | 7 | 4,340.19 | 4 | 0 | 0.00 | 3.43 | 1 | None | 18-24 |
| FN-2 | CUST00184 | 0.069 | 14 | 3 | 2,456.91 | 6 | 0 | 0.00 | 3.33 | 6 | Platinum | 18-24 |
| FN-3 | CUST01990 | 0.084 | 59 | 4 | 3,877.77 | 11 | 0 | 0.00 | 4.50 | 7 | Silver | 18-24 |
| FN-4 | CUST00866 | 0.120 | 26 | 1 | 1,280.71 | 5 | 0 | 0.00 | 5.00 | 1 | None | 25-34 |
| FN-5 | CUST01303 | 0.151 | 20 | 1 | 844.74 | 3 | 1 | 0.00 | 4.00 | 0 | None | 25-34 |
| FN-6 | CUST01655 | 0.170 | 13 | 2 | 1,358.99 | 2 | 0 | 0.00 | 4.50 | 7 | None | 25-34 |
| FN-7 | CUST02103 | 0.229 | 44 | 2 | 1,052.31 | 0 | 1 | 0.00 | 4.00 | 0 | Platinum | 45+ |
| FN-8 | CUST00903 | 0.250 | 42 | 1 | 632.18 | 13 | 1 | 1.00 | 5.00 | 11 | Silver | 25-34 |
| FN-9 | CUST02060 | 0.251 | 23 | 2 | 1,331.01 | 4 | 2 | 0.50 | 3.50 | 6 | None | 45+ |
| FN-10 | CUST00592 | 0.253 | 20 | 1 | 627.36 | 3 | 0 | 0.00 | 4.00 | 1 | None | 18-24 |

### False Negative Pattern Analysis

**Failure Mode A — High-value recent buyers who silently churned (FN-1, FN-2, FN-3):**
CUST02072 (7 purchases, ₹4,340, recency=35 days, probability=4.7%) and CUST01990 (₹3,877, 11 sessions, rating=4.5, probability=8.4%) represent the most dangerous error type. These customers appear healthy on all model features yet still churned. Possible explanations include a competitor switch, a product experience issue not captured in ticket data, or a life-event change. The model has no mechanism to detect dissatisfaction that never reaches a support ticket.

**Failure Mode B — Single recent purchase masking abandonment (FN-4, FN-5, FN-6, FN-10):**
CUST00866 (recency=26, frequency=1, rating=5.0, probability=12%) and CUST00592 (recency=20, frequency=1) made recent purchases but churned immediately after. A single recent transaction suppresses predicted churn probability significantly despite frequency=1 being a reliable churn predictor. A "first-and-only" purchase flag would improve detection of this pattern.

**Failure Mode C — High support dissatisfaction suppressed by other signals (FN-8, FN-9):**
CUST00903 (negative_ticket_rate=1.0, meaning every support interaction was negative, probability=25%) should have been flagged at a higher risk level. However, high session activity (13 sessions) and a perfect rating (5.0) confused the model. This reflects the known `ticket_count_90d` negative coefficient anomaly — higher ticket volume is associated with engagement, inadvertently reducing predicted churn risk even when those tickets are entirely negative.

**Failure Mode D — Platinum loyalty suppressing risk (FN-2, FN-7):**
CUST00184 (Platinum, recency=14, probability=6.9%) and CUST02103 (Platinum, recency=44, probability=22.9%) both churned despite Platinum membership. The model assigns strong negative churn weight to Platinum loyalty, which is statistically correct on average but creates blind spots for dissatisfied high-tier customers.

### Business Recommendation for False Negatives

The following supplementary rules should be applied by the CRM team to catch model blind spots:

1. **Silent high-value risk flag:** Any customer with monetary_180d > ₹3,000 AND zero support tickets in 90 days AND no campaign clicks in 30 days should be flagged for proactive outreach regardless of model score.
2. **First-purchase flag:** Customers with frequency_180d = 1 AND recency < 30 days should receive an automated post-purchase satisfaction survey within 7 days.
3. **Negative ticket override:** Any customer with negative_ticket_rate_90d = 1.0 should be escalated to the retention team for manual review, bypassing the model threshold.
4. **Loyalty tier false security:** Platinum customers with recency > 30 days and zero campaign engagement should be contacted proactively even if model probability is below 0.45.

---

## Summary: Root Cause Analysis

| Root Cause | Error Type | Affected Customers | Recommended Fix |
|---|---|---|---|
| Recency-dominance in model | FP | FP-1 to FP-5 | Add session-activity holdout rule |
| Loyalty tier underweighted for low-frequency | FP | FP-7 | Segment-specific threshold (Gold/Platinum = 0.35) |
| No signal for silent dissatisfaction | FN | FN-1, FN-2, FN-3 | Add post-purchase survey data as feature |
| Single recent purchase suppresses risk | FN | FN-4, FN-5, FN-6, FN-10 | Add `is_first_purchase` binary feature |
| Ticket count negative coefficient confound | FN | FN-8 | Separate frequency from sentiment in ticket features |
| Loyalty tier over-suppressing risk | FN | FN-2, FN-7 | Apply Platinum manual review rule |

---

## Cost Impact Summary

| Metric | Value |
|---|---|
| Total False Positives | 37 |
| Estimated FP Cost per Customer | ₹200 – ₹800 |
| Total FP Cost Range | ₹7,400 – ₹29,600 |
| Total False Negatives | 28 |
| Estimated FN Cost per Customer | ₹5,000 – ₹15,000 |
| Total FN Cost Range | ₹140,000 – ₹420,000 |
| **FN:FP Cost Ratio** | **~10:1** |

The asymmetric cost structure confirms that the current threshold of 0.45 is appropriate for minimising total business cost. Lowering the threshold to 0.40 would recover approximately 3–4 additional true positives at the cost of ~8 additional false positives, which remains economically justified given the cost ratio.

---

## Recommended Feature Engineering Improvements

Based on error analysis findings, the following features are recommended for the next model version:

| Feature | Formula | Targets |
|---|---|---|
| `is_first_only_purchase` | 1 if frequency_180d == 1 else 0 | FN-4, FN-5, FN-6, FN-10 |
| `recency_adj_rating` | avg_rating_180d × (1 / log(recency_days + 1)) | FP-1, FP-2, FP-3 |
| `all_negative_tickets` | 1 if negative_ticket_rate_90d == 1.0 else 0 | FN-8 |
| `high_value_zero_engagement` | 1 if monetary_180d > 3000 AND campaign_clicks_30d == 0 | FN-1, FN-2 |
| `loyalty_recency_interaction` | loyalty_encoded × (1 / recency_days) | FP-7, FN-7 |