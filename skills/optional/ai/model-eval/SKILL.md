# model-eval — Evaluate and Compare LLM Models

## When to Use
Sr AI uses this quarterly or when considering a model switch.

## Inputs
- Evaluation dataset (golden examples with expected outputs)
- List of model candidates to evaluate
- Current model performance baseline

## Procedure

1. Define or load evaluation dataset:
   - Golden examples with expected outputs
   - Cover all operation types (enrichment, conversation, extraction)
   - Minimum 20 examples per operation type
2. Run each model candidate against the full dataset
3. Measure per example:
   - Accuracy/quality score (1-5 scale)
   - Latency per call
   - Token usage (input + output)
4. Calculate per model:
   - Average quality score
   - P50 and P95 latency
   - Cost per call and projected monthly cost at current volume
5. Build comparison matrix
6. Post recommendation to #research:

```markdown
---
agent: sr-ai
type: proposal
severity: info
tags: [model-eval]
mentions: [@cto]
requires: review
---

## Model Evaluation

### Dataset: {count} examples across {operation types}

### Results:
| Model | Quality (1-5) | P50 Latency | P95 Latency | Cost/Call | Monthly Cost |
|-------|---------------|-------------|-------------|-----------|--------------|
{rows}

### Recommendation: {model name}
### Rationale: {why this model wins the quality x speed x cost trade-off}
### Cost Impact: {switching saves/costs $X/month}
### Quality Impact: {+/- change in quality score}
```

## Output Format
Model evaluation report posted to #research (see template above).

## Rules
- Model switch requires CTO approval
- Always include cost projection: "switching to model X saves/costs $Y/month at current volume"
- Never recommend a model based on benchmarks alone — use the project's golden dataset
- Quality regression > 10% is a hard blocker regardless of cost savings
