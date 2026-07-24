# churn-detect — Flag At-Risk Organizations

## When to Use
CS Lead uses this weekly or on trigger when engagement anomalies surface. Identifies orgs showing churn signals and classifies risk level.

## Inputs
- Org login data (last 21 days minimum)
- Weekly usage trends (3-week window)
- Error rate per org
- Onboarding completion status per org

## Procedure

1. Pull all active orgs and their usage data for the past 21 days
2. Evaluate each org against churn signals:
   - **No login 7+ days** — strong signal
   - **Declining usage trend** — 3 consecutive weeks of lower enrichments, conversations, or companies created
   - **High error rate** — errors affecting >10% of their operations
   - **Incomplete onboarding** — missing soul config, no first conversation, no first company
3. Classify risk per org:
   - **High**: 2+ strong signals or no login 14+ days
   - **Medium**: 1 strong signal or 2+ weak signals
   - **Low**: 1 weak signal, worth monitoring
4. Post churn alert to `#customer`, tag Account Mgr:

```markdown
---
agent: cs-lead
type: alert
severity: warning
tags: [churn-detect, weekly]
channel: #customer
mentions: [@account-mgr]
requires: action
---

## Churn Risk Alert — {date}

### High Risk
| Org | Last Login | Trend | Error Rate | Onboarding | Signals |
|-----|-----------|-------|------------|------------|---------|
| ... | ...       | ...   | ...        | ...        | ...     |

### Medium Risk
| ... |

### Low Risk
| ... |

### Recommended Actions
- {org}: {specific action}
```

## Output Format
Markdown alert posted to `#customer` with risk-classified org table and recommended actions per org.

## Rules
- High-risk orgs always tag Account Mgr for immediate follow-up
- Never assume churn — flag risk, let humans decide outreach
- Cross-reference with health-score for consistency
- If an org was recently onboarded (<14 days), use relaxed thresholds
