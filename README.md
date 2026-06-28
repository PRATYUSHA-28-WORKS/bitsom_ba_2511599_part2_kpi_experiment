# Campaign Experiment Analysis — Part 2: KPI Framework, A/B Test & Decision Recommendation

## Business Context

A subscription-based digital product company launched a new onboarding and activation campaign. Users were randomly split into two groups:
- **Control** (690 users): Existing onboarding experience
- **Treatment** (710 users): New campaign onboarding experience

Leadership needs to decide whether to roll this campaign out to all users.

---

## Dataset Description

**File:** `data/campaign_experiment_data.xlsx`  
**Total Records:** 1,408 raw (1,400 after deduplication)

| Column | Description |
|--------|-------------|
| user_id | Unique user identifier |
| signup_date | Date user signed up |
| experiment_group | Control or Treatment |
| region | Geographic region (North/South/East/West) |
| device_type | Desktop / Mobile / Tablet |
| traffic_source | Email / Organic / Paid Search / Referral / Social |
| plan_type | Free / Basic / Premium |
| visited_landing_page | Binary — visited landing page (1/0) |
| started_trial | Binary — started free trial (1/0) |
| completed_onboarding | Binary — completed onboarding (1/0) |
| converted_to_paid | Binary — converted to paid plan (1/0) |
| revenue_30d | Revenue generated in 30 days ($) |
| support_tickets_30d | Number of support tickets filed in 30 days |
| refund_requested | Binary — requested a refund (1/0) |
| days_to_convert | Days from signup to paid conversion (NaN if not converted) |
| engagement_score | Engagement score (0-100 scale) |

---

## Data Quality Checks and Handling

| Check | Finding | Action Taken |
|-------|---------|-------------|
| Duplicate user_ids | 8 duplicates found | Removed, kept first occurrence |
| Missing: device_type | 18 records | Filled with "Unknown" |
| Missing: traffic_source | 24 records | Filled with "Unknown" |
| Missing: engagement_score | 14 records | Imputed with column mean (57.85) |
| Missing: days_to_convert | 1,336 records | Expected — only converters have values; left as NaN |
| Invalid binary values | 0 | All binary columns are clean (0/1 only) |
| Revenue outliers | 2 users with revenue > $2,000 | Retained — legitimate high-value users |
| Segment balance | Region/Device/Plan proportions checked | Distributions similar across groups |

---

## North Star Metric

**Paid Conversion Rate** — the proportion of users who convert to a paid subscription within 30 days.

### Rationale
- Directly drives revenue — this is the gateway to monetization
- Binary and clean — suitable for reliable statistical testing
- Connects to long-term growth — each converted user enters the revenue base
- Cannot be trivially gamed without affecting guardrail metrics (refunds, support)

### Risk of Blind Optimization
If conversion is optimized in isolation, teams could use manipulative or high-pressure onboarding flows that boost short-term conversions but damage retention, increase refunds, and create negative word-of-mouth.

---

## KPI Tree Summary

```
Paid Conversion Rate (North Star)
├── FUNNEL ENGAGEMENT
│   ├── Landing Page Visit Rate
│   ├── Trial Start Rate
│   └── Onboarding Completion Rate
├── REVENUE QUALITY
│   ├── Avg Revenue Per User
│   ├── Avg Revenue Per Converted User
│   └── Days to Convert
└── EXPERIENCE HEALTH
    ├── Engagement Score
    ├── Segment-Level Conversion Rate
    └── Onboarding Drop-off Rate
        └── [Guardrails below]
            ├── Refund Rate
            ├── Support Ticket Rate
            └── Segment-Level Decline
```

See `outputs/kpi_tree.png` for the full visual tree.

---

## Experiment Analysis Approach

1. Loaded and cleaned dataset (deduplication, missing value handling)
2. Computed all required metrics for Control vs Treatment
3. Performed segment analysis by Region, Device Type, and Plan Type
4. Ran a Two-Proportion Z-Test on the North Star metric (Paid Conversion Rate)
5. Evaluated guardrail metrics for risk signals
6. Combined findings into a recommendation memo

---

## Hypothesis Test Summary

| Parameter | Value |
|-----------|-------|
| Test Type | One-tailed Two-Proportion Z-Test |
| Metric | Paid Conversion Rate |
| H₀ | p_treatment ≤ p_control |
| H₁ | p_treatment > p_control |
| Significance Level | α = 0.05 |
| Control Rate | 3.19% (22/690) |
| Treatment Rate | 7.04% (50/710) |
| Z-Statistic | 3.264 |
| P-Value | **0.0005** |
| Decision | **REJECT H₀** — Treatment is significantly better |

---

## Guardrail Metrics Considered

| Metric | Control | Treatment | Risk Assessment |
|--------|---------|-----------|-----------------|
| Refund Rate | 0.00% | 0.42% | Low — Monitor |
| Support Ticket Rate | 22.0% | 37.3% | **Moderate-High — Investigate** |
| Engagement Score | 57.0 | 62.9 | Positive |
| Days to Convert | 8.9 days | 6.4 days | Positive |

---

## Final Recommendation

**Launch the campaign to all users** — with the condition that the elevated support ticket rate in the Treatment group is diagnosed and addressed before or alongside rollout.

The conversion improvement (120.6% relative lift) is statistically significant and practically meaningful. All funnel metrics improved. Engagement is higher. Users convert faster. The only risk is the support ticket spike, which is operationally manageable.

---

## Assumptions and Limitations

- Random group assignment is assumed (not verified in this dataset)
- 30-day observation window may not capture long-term churn
- Revenue outliers in Control ($8,610 max) distort the average revenue per converted user — per-user revenue is more reliable
- Days to convert is based on only 64 converters total (directional only, not a statistically tested finding)
- Support ticket rate elevated in Treatment but root cause is unknown — further investigation needed

---

## Screenshots Included

| File | Contents |
|------|----------|
| `screenshots/summary_metrics.png` | Control vs Treatment comparison table |
| `screenshots/hypothesis_test_output.png` | Z-test setup, output, and decision |
| `screenshots/kpi_tree_preview.png` | KPI tree visualization |
