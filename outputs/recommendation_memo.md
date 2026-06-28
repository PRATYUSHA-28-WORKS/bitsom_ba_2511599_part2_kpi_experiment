# Recommendation Memo: Campaign Experiment Decision

**To:** Leadership Team  
**From:** Business Analyst  
**Date:** June 2025  
**Subject:** A/B Experiment Recommendation — New Onboarding & Activation Campaign

---

## Executive Summary

The new onboarding and activation campaign (Treatment) produced a **120.6% improvement in paid conversion rate** (3.19% → 7.04%), a result that is statistically significant (p = 0.0005). Engagement scores are higher in the Treatment group, and users convert faster. However, the support ticket rate is substantially elevated in the Treatment group (37.3% vs 22.0%), which represents a notable operational risk.

**Recommendation: Launch with targeted support improvements.**  
The conversion signal is strong and reliable. The support burden is a solvable operational issue that should not block launch, but must be actively addressed pre-rollout.

---

## Business Problem Statement

A subscription-based digital product company launched a new onboarding and activation campaign. Leadership needs to decide whether to roll out the campaign to all users. The decision impacts:

- **Product & Growth teams** — campaign design and funnel optimization
- **Customer Support** — capacity planning and ticket volume management
- **Finance** — revenue forecasting and customer acquisition cost
- **Users** — quality of the onboarding experience they receive

### Decision Criteria
| Requirement | Threshold |
|-------------|-----------|
| What metric should improve | Paid Conversion Rate (North Star) |
| Required statistical evidence | p-value < 0.05 (95% confidence) |
| Risks to monitor | Refund rate, support ticket rate, engagement score |

---

## North Star Metric

**Paid Conversion Rate** — the proportion of users who convert to a paid subscription within 30 days.

### Why This is the North Star

| Criterion | Reasoning |
|-----------|-----------|
| Direct business value | Revenue only exists when users convert; this is the gateway metric |
| Lagging but reliable | Unlike engagement (which could be gamed), paid conversion requires genuine intent |
| Connects to growth | Each percentage point improvement in conversion compounds across the entire user base |
| Manageable risk | Optimizing conversion alone is not dangerous as long as guardrail metrics are monitored |

### Why Other Metrics Are Supporting (Not North Star)

- **Engagement Score** — a leading indicator but does not guarantee revenue
- **Trial Start Rate** — a mid-funnel metric; many trials do not convert
- **Revenue Per User** — too noisy due to outliers; difficult to test cleanly
- **Onboarding Completion Rate** — upstream of conversion; necessary but not sufficient

### Risk of Blind Optimization

If conversion rate is optimized without guardrails, teams could create high-pressure or misleading onboarding flows that generate short-term conversions but lead to refunds, churn, or poor LTV. The refund rate and engagement score are essential guardrails.

---

## KPI Tree Summary

```
Paid Conversion Rate (North Star)
├── Funnel Engagement
│   ├── Landing Page Visit Rate
│   ├── Trial Start Rate
│   └── Onboarding Completion Rate
├── Revenue Quality
│   ├── Avg Revenue Per User
│   ├── Avg Revenue Per Converted User
│   └── Days to Convert
└── Experience Health
    ├── Engagement Score
    ├── Refund Rate [GUARDRAIL]
    ├── Support Ticket Rate [GUARDRAIL]
    └── Segment-Level Conversion Decline [GUARDRAIL]
```

See `outputs/kpi_tree.png` for the full visual KPI tree.

---

## Experiment Result Summary

| Metric | Control | Treatment | Lift |
|--------|---------|-----------|------|
| Users | 690 | 710 | — |
| Landing Page Visit Rate | 63.6% | 72.4% | +8.8pp |
| Trial Start Rate | 25.1% | 29.0% | +3.9pp |
| Onboarding Completion Rate | 15.7% | 21.1% | +5.5pp |
| **Paid Conversion Rate** | **3.19%** | **7.04%** | **+3.85pp (+120.6%)** |
| Avg Revenue Per User | $51.97 | $54.25 | +$2.28 |
| Avg Revenue Per Converted User | $1,123 | $430 | Lower (see note) |
| Engagement Score | 57.0 | 62.9 | +5.9 pts |
| Refund Rate | 0.00% | 0.42% | +0.42pp |
| Support Ticket Rate | 22.0% | 37.3% | +15.3pp |
| Avg Days to Convert | 8.9 days | 6.4 days | -2.5 days |

**Note on Revenue Per Converted User:** Control has only 22 converters vs 50 in Treatment. The higher Control ARPC is distorted by a small number of very high-value outliers ($8,610 max). Average revenue per total user ($54.25 vs $51.97) is the more reliable comparison and shows Treatment is slightly ahead.

---

## Hypothesis Test Interpretation

- **Test:** One-tailed Two-Proportion Z-Test
- **Z-Statistic:** 3.264
- **P-Value:** 0.0005
- **Significance Level:** 0.05
- **Conclusion:** Reject H₀ — the Treatment conversion rate is statistically significantly higher than Control.

The probability of observing this result by chance, assuming no real effect, is 0.05%. This is extremely strong evidence in favor of the Treatment.

---

## Guardrail Analysis

| Guardrail | Control | Treatment | Risk |
|-----------|---------|-----------|------|
| Refund Rate | 0.00% | 0.42% | **Low** — small absolute; 3 users requested refunds |
| Support Ticket Rate | 22.0% | 37.3% | **Moderate-High** — 69.5% relative increase |
| Engagement Score | 57.0 | 62.9 | **Positive** — Treatment users more engaged |
| Days to Convert | 8.9 | 6.4 | **Positive** — Treatment converts faster |

The support ticket rate is the primary concern. It may indicate the new onboarding flow generates confusion. This should be diagnosed (e.g., which step in onboarding causes ticket spikes?) and resolved before or alongside launch.

---

## Segment-Level Insight

**Region:** Treatment outperforms Control across all four regions (North, South, East, West). No segment shows a decline.

**Device:** Treatment conversion improvement is visible across Desktop, Mobile, and Tablet. Mobile shows the largest absolute lift.

**Plan Type:** The largest conversion lift is observed for Free plan users — the primary target audience for the campaign. Premium plan users show modest lift, consistent with already being higher-intent.

No segment shows a guardrail risk specific to that segment alone; the support ticket elevation is global.

---

## Final Recommendation

**Launch the campaign to all users**, with the following conditions:

1. **Diagnose support spike before launch** — identify which onboarding step is driving additional support tickets. This may be addressable with in-app guidance or FAQ improvements.
2. **Monitor refund rate weekly** for the first 30 days post-launch
3. **Set up real-time dashboards** tracking conversion rate, support ticket rate, and engagement score by region and device
4. **Retain the A/B test design** for the first 2 weeks post-launch to catch unexpected regressions at scale

---

## Risks and Limitations

| Risk | Mitigation |
|------|------------|
| Support ticket volume scales with full rollout | Pre-invest in support capacity and self-serve resources |
| 30-day window may not capture churn post-conversion | Track 60/90-day retention in follow-up analysis |
| Revenue outliers inflate Control ARPC | Use median revenue or per-user revenue for ongoing monitoring |
| Sample size imbalance (690 vs 710) | Negligible; both groups are adequately powered |
| Days-to-convert sample very small (64 users total) | Use as directional signal, not definitive finding |

---

## Next Steps

1. Investigate support ticket root cause (this week)
2. Implement support improvements (within 2 weeks)
3. Gradual rollout to 100% of users with monitoring dashboards
4. 30-day post-launch review: conversion, retention, refund, and support metrics
5. Consider follow-up experiment isolating which campaign element drives the lift
