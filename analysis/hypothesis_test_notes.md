# Hypothesis Test Notes — Campaign Experiment Analysis

## Metric Being Tested

**Paid Conversion Rate** — the proportion of users in each group who converted to a paid subscription within the 30-day observation window.

### Why This Metric Was Selected

Paid Conversion Rate is the North Star metric for this experiment. It directly captures the primary business objective: converting free or trial users into paying customers. It is more reliable than revenue for hypothesis testing because revenue data contains outliers that can inflate variance and obscure real differences. Conversion rate is a clean binary outcome (0 or 1 per user), making it well-suited for proportion-based statistical tests.

---

## Hypotheses

**Null Hypothesis (H₀):**  
The paid conversion rate in the Treatment group is equal to or less than the paid conversion rate in the Control group.  
`H₀: p_treatment ≤ p_control`

**Alternate Hypothesis (H₁):**  
The paid conversion rate in the Treatment group is greater than the paid conversion rate in the Control group.  
`H₁: p_treatment > p_control`

---

## Test Design

| Parameter | Value |
|-----------|-------|
| Test Type | One-tailed, Two-Proportion Z-Test |
| Direction | Right-tailed (testing if Treatment > Control) |
| Significance Level (α) | 0.05 |
| Confidence Level | 95% |
| Minimum Detectable Effect | Any statistically significant uplift |

### Why One-Tailed?

The business question is directional: "Does the new campaign improve conversion?" There is no interest in whether Treatment performs worse; a worse outcome would simply lead to rejection of the launch. A one-tailed test is appropriate and gives more statistical power to detect a genuine improvement.

### Why Z-Test (Proportion Test)?

- The outcome variable (converted_to_paid) is binary (0 or 1)
- Sample sizes are large (n_control = 690, n_treatment = 710), satisfying the normal approximation condition
- We are comparing two independent groups (random assignment)

---

## Test Inputs

| Input | Value |
|-------|-------|
| Control Users (n_c) | 690 |
| Treatment Users (n_t) | 710 |
| Control Conversions | 22 |
| Treatment Conversions | 50 |
| Control Conversion Rate (p_c) | 0.0319 (3.19%) |
| Treatment Conversion Rate (p_t) | 0.0704 (7.04%) |
| Pooled Proportion (p_pool) | 0.0514 |

---

## Test Calculation

```
Pooled p = (22 + 50) / (690 + 710) = 72 / 1400 = 0.0514

Standard Error = sqrt(p_pool × (1 - p_pool) × (1/n_c + 1/n_t))
              = sqrt(0.0514 × 0.9486 × (1/690 + 1/710))
              = sqrt(0.04876 × 0.002860)
              = 0.01181

Z = (p_t - p_c) / SE
  = (0.0704 - 0.0319) / 0.01181
  = 0.0385 / 0.01181
  = 3.264
```

---

## Test Output

| Output | Value |
|--------|-------|
| Z-Statistic | 3.264 |
| P-Value | 0.0005 |
| Critical Value (α = 0.05) | 1.645 |
| Decision | **REJECT H₀** |

---

## Decision Rule

- If p-value < 0.05 → Reject H₀ (Treatment is significantly better)
- If p-value ≥ 0.05 → Fail to reject H₀ (Insufficient evidence)

**Result:** p-value = 0.0005, which is far below α = 0.05.  
**Decision: Reject H₀** — the Treatment group's conversion rate is statistically significantly higher than the Control group's.

---

## Business Interpretation

The new onboarding campaign (Treatment) produced a **120.6% relative improvement** in paid conversion rate (from 3.19% to 7.04%). This difference is statistically significant at the 95% confidence level (p = 0.0005). The probability of observing this result by random chance if there were no true effect is only 0.05%.

This is strong statistical evidence that the new campaign meaningfully improves user conversion. However, before recommending a full launch, guardrail metrics must also be evaluated to ensure no unintended side effects (e.g., elevated support burden or refund rate).

---

## Guardrail Metric Evaluation

| Guardrail Metric | Control | Treatment | Risk Level |
|-----------------|---------|-----------|------------|
| Refund Rate | 0.00% | 0.42% | Low (small absolute, monitor) |
| Support Ticket Rate | 22.0% | 37.3% | **Moderate-High — needs investigation** |
| Avg Engagement Score | 57.03 | 62.94 | Positive — no risk |
| Avg Days to Convert | 8.86 | 6.40 | Positive — Treatment converts faster |

### Key Observation on Support Tickets

The support ticket rate in the Treatment group is 69.5% higher (37.3% vs 22.0%). This is the most concerning guardrail finding. It may indicate the new onboarding experience is confusing some users, generating friction. This does not invalidate the conversion lift, but it does represent an operational cost and a potential satisfaction risk that should be addressed before or alongside a full rollout.

---

## Assumptions and Limitations

- Random assignment to groups is assumed
- 30-day observation window may not capture long-term churn after conversion
- Outliers in revenue (max $8,610 in Control) could affect revenue-based tests but not proportion tests
- Days to convert is only available for 64 users (50 Treatment + 14 Control), making it a directional signal rather than a statistically tested result
